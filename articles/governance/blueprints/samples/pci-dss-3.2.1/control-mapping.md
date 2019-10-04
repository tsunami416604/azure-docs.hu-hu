---
title: Minta-PCI-DSS v 3.2.1 terv – vezérlés leképezése
description: A Payment Card Industry adatbiztonsági standard v 3.2.1 tervezetének vezérlése a Azure Policy és a RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2ec299a79f852c553763439290b014a91d3a9414
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918604"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v 3.2.1 Blueprint-minta leképezésének vezérlése

A következő cikk azt ismerteti, hogyan jelennek meg az Azure-tervrajzok PCI-DSS v 3.2.1 terv mintája a PCI-DSS v 3.2.1 vezérlőkhöz. További információ a vezérlőelemekről: [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

A következő leképezések a **PCI-DSS v 3.2.1:2018-** as vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a szabályzatot a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki  **\[az\] előzetes naplózási PCI v 3.2.1:2018 vezérlőt, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 és 1.3.4 határ védelme

Ez a terv segítséget nyújt a hálózatok kezelésében és szabályozásában olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek megengedő szabályokkal figyelik a hálózati biztonsági csoportokat. A túl megengedhető szabályok lehetővé tehetik a nem kívánt hálózati hozzáférést, és azt felül kell vizsgálni. Ez a terv egy olyan Azure Policy-definíciót rendel hozzá, amely figyeli a nem védett végpontokat, az alkalmazásokat és a Storage-fiókokat. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h és 6.5.3 titkosítási védelem

Ez a terv segít kikényszeríteni a szabályzatot a titkosítási vezérlők használatával [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek bizonyos titkosítási vezérlőket kényszerítenek, és a gyenge titkosítási beállítások naplózási funkcióit használják. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, az ehhez a tervhez hozzárendelt szabályzatok transzparens adattitkosítást igényelnek az SQL-adatbázisokban; a Storage-fiókok és az Automation-fiókok változóinak naplózása. Léteznek olyan szabályzatok is, amelyek a Storage-fiókok, a Function apps, a WebApp, a API Apps és a Redis Cache nem biztonságos kapcsolatait naplózzák, és naplózzák a titkosítatlan Service Fabric kommunikációt.

- Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az Automation-fiók változóit titkosítani kell
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az SQL DB transzparens adattitkosításának üzembe helyezése

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 és 11.2.1 sebezhetőségi vizsgálat és rendszerfrissítések

Ez a terv a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL-biztonsági réseket és a virtuális gépek Azure-beli biztonsági réseit figyelő [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével segíti a biztonsági rések kezelését Security Center. A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára.

- Hiányzó Endpoint Protection figyelése Azure Security Center
- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- A számítógépekre telepíteni kell a rendszerfrissítéseket
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. a 7.1.2 és a 7.1.3 elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. Az előfizetés tulajdonosi engedélyeinek kezelése segítséget nyújt a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a és 8.3.1. b privilegizált hozzáférési jogosultságok kezelése

Ebből a tervből megtudhatja, hogyan korlátozhatja és szabályozhatja a privilegizált hozzáférési jogosultságokat, ha [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá a külső fiókoknak a tulajdonossal, írási és/vagy olvasási engedélyekkel, valamint a tulajdonossal és/vagy írási engedélyekkel rendelkező alkalmazott fiókokkal. a többtényezős hitelesítés engedélyezve van. Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz. Az egyéni RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni RBAC-szabályok hibásak. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata leegyszerűsíti az engedélyek kezelését, és központosítja az adatbázis-felhasználók és más Microsoft-identitások felügyeletét  
Services.
 
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Egyéni RBAC-szabályok használatának naplózása

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 és 8.1.5 a felhasználói hozzáférési jogok minimális jogosultsága és felülvizsgálata

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a emelt szintű engedélyekkel rendelkező külső fiókokat.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 eltávolítása vagy a hozzáférési jogosultságok módosítása

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Active Directory és a RBAC használatával frissítheti a felhasználói szerepköröket a szervezeti változások tükrözése érdekében. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b és 8.2.5 jelszó alapú hitelesítés

Ez a terv segít az erős jelszavak betartatásában olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem érvényesítő Windows-virtuális gépeket naplózzák. A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- \[Előzetes\]verzió: A maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózása 70 nap
- \[Előzetes\]verzió: Követelmények telepítése a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózásához 70 nap
- \[Előzetes\]verzió: A jelszó minimális hosszát 14 karakterre nem korlátozó Windows-alapú virtuális gépek naplózása
- \[Előzetes\]verzió: Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakternél
- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózása
- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózására vonatkozó követelmények telepítése

## <a name="103-and-1054-audit-generation"></a>10,3 és 10.5.4 naplózási generáció

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével.
A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre. Az Azure-naplók a szinkronizált belső órákat használják az események időbeli korrelációs rekordjának létrehozásához az erőforrások között.

- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra

## <a name="1236-and-1237-information-security"></a>12.3.6 és 12.3.7-adatok biztonsága

Ez a terv segít a hálózat kezelésében és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek naplózzák az elfogadható hálózati telephelyeket és a környezet számára engedélyezett jóváhagyott vállalati termékeket. Ezek az egyes vállalatok által testreszabhatók az egyes szabályzatok házirend-paraméterei között.

- Engedélyezett helyek
- Erőforráscsoportok számára engedélyezett helyek

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a PCI-DSS v 3.2.1 terv vezérlési leképezését, a következő cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 terv – áttekintés](./index.md)
> [PCI-DSS v 3.2.1 Blueprint – lépések üzembe helyezése](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.