---
title: Mi az az Azure Australia? | Microsoft Docs
description: Útmutató az Azure az ausztráliai régiókban való konfigurálásához az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek kielégítése érdekében.
author: Galey801
ms.service: azure-australia
ms.topic: overview
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a6bd4a8747ba4aa592914fc970acd756803dd15e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570879"
---
# <a name="what-is-azure-australia"></a>Mi az az Azure Australia?

Az Azure 2014-ben indult el Ausztráliában, két régióval; Kelet-Ausztrália (Sydney) és Délkelet-Ausztrália (Melbourne). Április 2018-án két új Azure-régiót indítottunk a Canberraban – Ausztrália középső és ausztráliai középső régiója 2. Az ausztráliai közép-és ausztráliai Közép-2 régiókat szándékosan a kormányzati és a kritikus nemzeti infrastruktúra igényeihez terveztük, és speciális kapcsolódást és rugalmasságot kínál, így a rendszerek a felhő mellett is megtalálhatók, a következő szintekkel a biztonság és a rugalmasság csak a titkos besorolású hálózatok esetében várható. Az Azure Australia olyan platform, amely a kormányzati és a kritikus fontosságú nemzeti infrastruktúra digitális átalakítására szolgál – és az Ausztráliában elérhető egyetlen kritikus fontosságú felhő, amelyet kifejezetten ezekre az igényekre terveztek.

Bizonyos ausztráliai kormányzati követelmények vonatkoznak a [Microsoft Azure Ausztráliában](https://azure.microsoft.com/global-infrastructure/australia/) az ausztráliai kormányzati adatokra és rendszerekre való csatlakozásra,-felhasználásra és-üzemeltetésre. Az ezen a lapon található erőforrások az összes ügyfélre vonatkozó általános útmutatást nyújtanak, amely a biztonságos konfigurációra és működésre koncentrál.

Tekintse meg a [Microsoft szolgáltatás-megbízhatósági portál](https://aka.ms/au-irap) Ausztrália oldalát, amely az Azure Australia Information Security Registered ASSESSMENT (IRAP) felmérése, minősítése és bevonása a Certified Cloud Services listáról (CCSL) című témakörben található. Az ausztráliai oldalon a kormányzati és a kritikus fontosságú infrastruktúra-szolgáltatókra vonatkozó egyéb Microsoft-tanácsokat is talál.

## <a name="principles-for-securing-customer-data-in-azure-australia"></a>Az ügyféladatok biztonságossá tételének alapelvei az Azure Ausztráliában

Az Azure Australia számos szolgáltatást és szolgáltatást kínál, amelyek segítségével felhőalapú megoldásokat hozhat létre a szabályozott/ellenőrzött adatszükségletek kielégítése érdekében. A megfelelő ügyfél-megoldás nem sokkal több, mint a beépített Azure Australia-képességek hatékony megvalósítása, amely egy megbízható adatbiztonsági gyakorlattal párosul.

Ha megoldást üzemeltet az Azure Ausztráliában, a Microsoft számos követelményt kezel a felhő-infrastruktúra szintjén.

Az alábbi ábrán az Azure védelmi részletes modellje látható. Például a Microsoft alapvető felhőalapú infrastruktúrát biztosít a DDoS-hez, valamint az ügyfelek képességeivel, például a biztonsági készülékekkel vagy a prémium DDoS-szolgáltatásokkal az ügyfél-specifikus alkalmazások igényeihez.

![helyettesítő szöveg](media/defenceindepth.png)

Ezek a cikkek ismertetik a szolgáltatások és alkalmazások biztonságossá tételéhez szükséges alapelveket, valamint útmutatást és ajánlott eljárásokat tartalmaznak az alapelvek alkalmazásához. Ez azt jelenti, hogy az ügyfeleknek az Azure Australia intelligens használatát kell használniuk ahhoz, hogy megfeleljenek a kormányzati érzékeny és minősített információkat kezelő megoldásokhoz szükséges kötelezettségeknek és felelősségeknek.

Az ausztrál kormányzati szerveknek az Azure-ba való migrálása két kategóriába tartozik.

## <a name="security-in-azure-australia"></a>Biztonság az Azure Ausztráliában

Az identitás, a szerepköralapú hozzáférés-vezérlés, a titkosításon és a Rights managementen keresztüli adatvédelem, valamint a hatékony figyelési és konfigurálási szabályozás a megvalósításához szükséges kulcsfontosságú elemek. Ebben a szakaszban számos cikk ismerteti az Azure beépített képességeit, valamint azt, hogy azok hogyan kapcsolódnak az ISM és az ASD Essential 8 rendszerhez.

Ezek a cikkek a *fogalmak – > biztonság az Azure Australian*menüpont alatt érhetők el.

## <a name="gateways-in-azure-australia"></a>Átjárók az Azure Ausztráliában

A kormányzati szervek egy másik kulcsfontosságú lépése a peremhálózat biztonsági képességeinek kialakítása. Ezeket a képességeket biztonságos internetes átjáróknak (SIG) nevezzük, és az Azure-t használva az Ön felelőssége, hogy biztosítsa ezeket a védelmet. A Microsoft nem végez SIG-t; az összes ügyfelet védő peremhálózati hálózati szolgáltatások, valamint az Azure-környezetben üzembe helyezett speciális szolgáltatások kombinálásával azonban egyenértékű képességet biztosíthat.

Ezek a cikkek az *Azure Australia fogalmak-> átjárói*menüpontjában érhetők el.

## <a name="next-steps"></a>További lépések

* Ha a fő hangsúly az Azure-ban lévő adatai védelmére szolgál, [](secure-your-data.md) kezdje az adatbiztonsággal
* Ha a legfontosabb, hogy az Azure-beli átjárót fejlesszen, indítsa el az [átjáró naplózását, a naplózást és](gateway-log-audit-visibility.md)a láthatóságot.
