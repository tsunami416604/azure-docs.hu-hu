---
title: A biztonságot az Azure Security Center javaslatokat |} A Microsoft Docs
description: " Ismerje meg a biztonsági szabályzatok és javaslatok az Azure Security Center használata a biztonsági támadások történésekről. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 3640e4affe42986106791cba50f6cbfd97906806
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308324"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>A biztonságot az Azure Security Center-javaslatok
Jelentős biztonsági esemény esélyét csökkentheti biztonsági szabályzat konfigurálásával, majd az Azure Security Center által nyújtott ajánlások megvalósítása. Ez a cikk bemutatja, hogyan biztonsági szabályzatok és javaslatok használata a Security Centerben a biztonsági támadások történésekről.

> [!NOTE]
> Ez a cikk épül, amely a szerepkörök és a Security Center az bemutatott fogalmakkal [tervezéséhez és útmutató](security-center-planning-and-operations-guide.md). Akkor célszerű, hogy tekintse át a tervezési útmutató a folytatás előtt.
>
>

## <a name="managing-security-recommendations"></a>Biztonsági javaslatok kezelése
Biztonsági szabályzat határozza meg, az adott előfizetésen vagy erőforráscsoporton belüli erőforrások ajánlott vezérlők. A Security Centerben a vállalat biztonsági követelményeinek megfelelően szabályzatokat definiálhat. További tudnivalókért lásd: [biztonsági szabályzatok beállítása a Security Center](security-center-azure-policy.md).

Erőforráscsoportokra vonatkozó biztonsági szabályzatok örökli az előfizetés szintjéről.

![Biztonsági házirend öröklése][1]

Ha egy adott erőforráscsoportban egyedi szabályzatokat kíván, az erőforráscsoport az öröklés funkciót letilthatja. Letiltani, öröklés beállítása a biztonsági szabályzat panel egyedi, és testre szabhatja, amely a Security Center javaslatait az mutatja be.

Például ha az SQL Database transzparens adattitkosítási (TDE) házirend nem igénylő számítási feladatokhoz, kapcsolja ki a szabályzatot az előfizetés szintjén, és engedélyezni kell azt csak az erőforráscsoportoknál, ha az SQL TDE szükség.

> [!NOTE]
> Az előfizetés szintű és az erőforráscsoport szintű szabályzat közötti ütközés esetén az erőforráscsoport szintű szabályzat élvez elsőbbséget.
>
>

A Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, a vezérlők, állítsa be a biztonsági szabályzatban alapuló hoz létre. A javaslatok végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán.

Aktuális szabályzatokra vonatkozó ajánlások a Security Center fókuszban a Rendszerfrissítések, az operációs rendszer konfigurációja, hálózati biztonsági csoportok alhálózatokhoz és virtuális gépek (VM), SQL Database naplózási szolgáltatása, az SQL Database TDE, és a webalkalmazás-tűzfalak. A Security Center javaslatainak legfrissebb lefedettségét, lásd: [a Security Centerben a biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="scenario"></a>Forgatókönyv
A forgatókönyv bemutatja, hogyan használhatja a Security Center csökkentik a jelentős biztonsági incidensek és a műveletek figyelése a Security Center javaslatait. A forgatókönyv használja a fiktív vállalat, a Contoso és a szerepkörök a Security Center megjelenő [tervezéséhez és útmutató](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). A szerepkörök olyan, sok ember és csoport, amely a Security Center különböző biztonsági feladatok végrehajtására. A szerepkörök a következők:

![A forgatókönyv-szerepkörök][2]

A Contoso nemrég áttelepített néhány, a helyszíni erőforrások az Azure-bA. A Contoso biztosítani szeretné, bevezetéséhez és karbantartásához, amely az a felhőbeli erőforrások biztonsági támadásokkal szemben a sebezhetőség csökkentése védelmet.

## <a name="recommended-solution"></a>Javasolt megoldás
Egy megoldás, ha a Security Center segítségével történő megakadályozása és a biztonsági rések észlelése. Contoso hozzáféréssel rendelkezik a Security Center használatával Azure-előfizetésében. A [ingyenes szint](security-center-pricing.md) a Security Center automatikusan engedélyezve van minden Azure-előfizetés és az adatgyűjtés engedélyezése az előfizetés minden virtuális gépen.

David, a Contoso IT Security, konfigurálja a **biztonsági házirend** a Security Center használatával. A Security Center elemzi a Contoso Azure-erőforrások biztonsági állapotát. A Security Center azonosítja a potenciális biztonsági réseket, amikor létrehozza **javaslatok** a vezérlőket, állítsa be a biztonsági szabályzat alapján.

Jeff, a felhőbeli munkaterhelés tulajdonosával, megvalósítása és kezelése a Contoso-biztonsági házirendek megfelelően védelmet a felelős. Jeff figyelheti a Security Center által létrehozott javaslatok funkciókat a alkalmazni. A javaslatok végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán Jeff.

Jeff megvalósítása és kezelése a védelmi és biztonsági rések kiküszöbölése ahhoz hogy kell:

- Monitorozása a Security Center által biztosított biztonsági javaslatok
- Biztonsági javaslatok értékeli, és döntse el, ha azt kell alkalmazni, vagy hagyja figyelmen kívül
- Biztonsági javaslatok alkalmazása

Nézzük lépésekkel Jeff tekintse meg, hogyan használja a Security Center javaslatokat őt biztonsági rések kiküszöbölése vezérlők konfigurálásának folyamatán keresztül irányítja.

## <a name="how-to-implement-this-solution"></a>A megoldás megvalósítása
Bejelentkezik Jeff [az Azure portal](https://azure.microsoft.com/features/azure-portal/) , és megnyitja a Security Center konzolján. Napi figyelési tevékenysége részeként a Phil ellenőrzi, hogy van-e biztonsági javaslatokat a következő lépések végrehajtásával:

1. Jeff kiválasztja a **javaslatok** csempére kattintva nyissa meg a **javaslatok**.
   ![Válassza a javaslatok][3]
2. Jeff áttekinti a javaslatok listája. Látja, hogy a Security Center adta-e a prioritásuk szerinti sorrendben történik, a legkisebb prioritás a legmagasabb prioritású javaslatok listája. Eldönti, hogy egy magas prioritású javaslatot a listában. Kiválasztja **Endpoint Protection telepítése** alatt **javaslatok**.
3. **Az Endpoint Protection telepítése** megnyílik a virtuális gépek listájának megjelenítése nélkül kártevőirtó engedélyezve van. Jeff megvizsgálja a virtuális gépek listáját, kiválasztja az összes virtuális gépet, majd kiválasztja **3 virtuális gépek telepítése**.
   ![Végpontvédelem telepítése][4]
4. **Válassza ki az Endpoint Protection** megnyílik a Jeff két kártevőirtó megoldások biztosítása. Jeff kiválasztja a **Microsoft Antimalware** megoldás.
5. A kártevőirtó megoldás kapcsolatos további információk is megjelennek. Jeff kiválasztja **létrehozás**.
   ![A Microsoft kártevőirtó][5]
6. Jeff kerül, a szükséges konfigurációs beállítások alatt **telepítése** , és kiválasztja **OK**.

[A Microsoft Antimalware](../security/azure-security-antimalware.md) aktív, a kiválasztott virtuális gépeken.

Jeff továbbra is a magas prioritású és a közepes prioritású javaslatok, haladjon át végrehajtására vonatkozó döntéseket. Jeff hivatkozik a [biztonsági javaslatok kezelése](security-center-recommendations.md) a cikkből megismerheti a javaslatok, és mindegyik funkciója alkalmazza, ha.

Jeff, amely megtanulja [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) hajt végre, válassza ki a biztonsági figyelése az Azure-hálózat és az infrastruktúra és a harmadik felektől származó threat intelligence és visszaélések panaszok kap. Ha Bálint biztonsági kapcsolattartói adatok biztosít a Contoso Azure-előfizetés, Microsoft-ügyfelek Contoso, ha a MSRC deríti fel, hogy a Contoso vásárlói adatokat egy jogosulatlan vagy illetéktelen fél hozzáfért. Jeff hozzunk kövesse alkalmazza a **biztonsági kapcsolattartói adatok megadása** javaslat (az adott javaslat a súlyosság a közepes a fenti javaslatok listája).

1. Jeff kiválasztja **biztonsági kapcsolattartói adatok megadása** alatt **javaslatok**, ekkor **biztonsági kapcsolattartói adatok megadása**.
2. Jeff választja, adja meg a kapcsolattartási adatokat az Azure-előfizetés. Egy második **biztonsági kapcsolattartói adatok megadása** panel nyílik meg.
   ![Biztonsági kapcsolattartói adatok][6]
3. A **biztonsági kapcsolattartói adatok megadása**, Jeff kerül:

  - a biztonsági kapcsolattartó e-mail-címek (nem áll adja meg e-mail-címek száma korlátozva) elválasztva
  - egy biztonsági telefonszám

4. Jeff is a beállítás bekapcsolása **küldése e-mailt küld riasztásokról** magas súlyossági szintű riasztások e-mailek fogadásához.
5. Jeff kiválasztja **OK** biztonsági kapcsolattartási alkalmazni a Contoso-előfizetés.

Végül a Jeff áttekinti az alacsony prioritású javaslatok **szervizelje az operációs rendszerek sebezhetőségeinek** , és meghatározza, hogy ez a javaslat nem alkalmazható. Szeretné elvetni a javaslatot. Jeff választja ki, hogy a jobb oldalon megjelenő három pontra, majd ezután kiválasztja az **Elvetés**.
   ![Az ajánlás bezárása][7]

## <a name="conclusion"></a>Összegzés
Figyelési javaslatok a Security Center segíthetnek a ki a biztonsági réseket, mielőtt a támadás akkor fordul elő. Biztonsági incidensek megakadályozása megvalósítása és kezelése a védelmet a Security Center biztonsági házirendeknek.

## <a name="next-steps"></a>További lépések
Ebben a forgatókönyvben a biztonsági szabályzatok és javaslatok használata a Security Centerben a biztonsági támadások történésekről mutatott. Tekintse meg a [incidensmegoldási forgatókönyvhöz](security-center-incident-response.md) megtudhatja, hogyan lehet egy incidensmegoldási terv támadás megtörténése előtt.

A Security Centerrel kapcsolatos további tudnivalókért lásd:

* [Biztonsági állapotfigyelés](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Figyelés és a biztonsági események feldolgozása](security-center-events-dashboard.md) – ismerje meg, hogyan figyelheti és a biztonsági események feldolgozása folyamatosan gyűjti.
* [Partneri megoldások monitorozása](security-center-partner-solutions.md) – útmutató a partnermegoldások állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
