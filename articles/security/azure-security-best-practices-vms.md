---
title: "Azure virtuális gép ajánlott biztonsági eljárások |} Microsoft Docs"
description: "Ez a cikk számos ajánlott biztonsági eljárások használhatók az Azure-ban található virtuális gép."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 1af02c90c6a97bed612903de438b4d8c26be19b6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Ajánlott eljárások az Azure virtuális gép biztonsági

A legtöbb szolgáltatott infrastruktúra (IaaS) szolgáltatás forgatókönyvek esetén [Azure virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/) a fő munkaterhelés felhő használó szervezetek számítástechnikai vannak. A tény, különösen akkor egyértelmű [hibrid forgatókönyvek](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) szervezetek, ahová a lassan munkaterheléseket telepít át a felhőbe. Ilyen esetben hajtsa végre a [általános biztonsági szempontok az infrastruktúra-szolgáltatási](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), és az ajánlott biztonsági eljárások alkalmazása a virtuális gépek.

Ez a cikk ismerteti a különböző virtuális gép ajánlott biztonsági eljárások, minden egyes származik a felhasználók saját virtuális gépek közvetlen felhasználói.

Az ajánlott eljárások a véleményét az együttműködési alapulnak, és együttműködik a jelenlegi Azure platform olyan képességeit, és a szolgáltatáskészletek. Mivel vélemények és technológiák idővel megváltozhat, módosítani fogjuk ebben a cikkben rendszeresen ezekkel a módosításokkal.

Minden egyes ajánlott eljárás a cikk ismerteti:

* Mi az ajánlott eljárás az.
* Miért célszerű engedélyezni.
* Hogyan szerezhet az engedélyezéshez.
* Mi történne, ha nem engedélyezi.
* Az ajánlott eljárás szerint lehetséges alternatívák.

A cikk a következő virtuális gép ajánlott biztonsági eljárások megvizsgálja:

* Virtuális gép hitelesítési és hozzáférés-vezérlés
* Virtuális gép rendelkezésre állási és hálózati hozzáférés
* Titkosítási kényszerítésével inaktív adatok a virtuális gépek védelme
* A virtuális gép frissítések kezelése
* A virtuális gép biztonságot kezelése
* Virtuális gép teljesítményének figyelése

## <a name="vm-authentication-and-access-control"></a>Virtuális gép hitelesítési és hozzáférés-vezérlés

A virtuális gép védelmének első lépése annak érdekében, hogy csak a hitelesített felhasználóknak beállíthatja az új virtuális gépek. Használhat [Azure házirendek](../azure-policy/azure-policy-introduction.md) egyezmények erőforrások létrehozására a szervezetben, létrehozzon testreszabott házirendeket, és ezek a házirendek alkalmazása erőforrások, például a [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md).

Természetesen az erőforráscsoporthoz tartozó virtuális gépek a házirendek jelentik. Bár javasolt ezt a módszert használja a virtuális gépek kezelése, is is elérését Ön szabályozza az egyes Virtuálisgép-házirendek használatával [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md).

Ha engedélyezi az erőforrás-kezelő házirendek és a virtuális gép hozzáférés-vezérlésének RBAC, tökéletesítése általános virtuális gép biztonsági. Azt javasoljuk, hogy Ön konszolidálhatják virtuális gépek azonos életciklusával ugyanabban az erőforráscsoportban. Erőforráscsoport-sablonok használatával telepítheti, figyeléséhez és költségek az erőforrások számlázási összesítő. Ahhoz, hogy a felhasználók számára a hozzáférést, és állítsa be a virtuális gépek, használja a [legalacsonyabb jogosultsági megközelítés](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). És jogosultságok hozzárendelése felhasználókhoz, ha tervezi, hogy a következő beépített Azure szerepkörök:

- [Virtuális gép közreműködő](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): kezelheti a virtuális gépek, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak.
- [Klasszikus virtuális gép közreműködő](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): kezelheti a klasszikus üzembe helyezési modellel, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez a virtuális gépek csatlakoznak használatával létrehozott virtuális gépeket.
- [Biztonságkezelő](../active-directory/role-based-access-built-in-roles.md#security-manager): kezelhetik biztonsági összetevők, a biztonsági házirendek és a virtuális gépek.
- [DevTest Labs felhasználói](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): mindent megtekinthetnek, és csatlakozzon, elindítható, indítsa újra, és állítsa le a virtuális gépek.

Ne ossza meg fiókkal és jelszóval egymástól a rendszergazdákat, és ne használja ugyanazt a jelszót több felhasználói fiókhoz vagy szolgáltatáshoz, különösen a közösségi oldalakon jelszavak vagy más nem felügyeleti tevékenységek. Ideális esetben használjon [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) sablonok biztonságosan beállítása a virtuális gépek. Ezt a módszert használja, a központi telepítési beállítások megerősítése és a biztonsági beállításainak a telepítés során.

A szervezeteknek, amelyek kényszeríti ki az adatok-hozzáférés-vezérlés RBAC képesség kihasználásával előfordulhat, hogy engedélyező felhasználóik szükségesnél több jogosultsággal. Bizonyos adatok nem megfelelő felhasználók hozzáférését közvetlenül kedvezőtlenül befolyásolhatja a adatokat.

## <a name="vm-availability-and-network-access"></a>Virtuális gép rendelkezésre állási és hálózati hozzáférés

Ha a virtuális gép magas rendelkezésre állású rendelkeznie kell kritikus alkalmazásokat futtat, javasoljuk, hogy több virtuális gép használja. A jobb rendelkezésre állás érdekében legalább két virtuális gépe létrehozása a [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md).

[Az Azure terheléselosztó](../load-balancer/load-balancer-overview.md) is szükséges, hogy elosztott terhelésű virtuális gépek ugyanabban a rendelkezésre állási csoportba tartozik. Ha az interneten érhető el a virtuális gépeken, konfigurálnia kell egy [internetre terheléselosztó](../load-balancer/load-balancer-internet-overview.md).

Amikor a virtuális gépek az interneten vannak kitéve, fontos, hogy [hálózati biztonsági csoportokkal (NSG-k) a hálózati adatforgalom szabályozásához](../virtual-network/virtual-networks-nsg.md). NSG-ket alhálózatokra alkalmazható, mert minimalizálhatja az NSG-k száma alhálózat szerint csoportosítja az erőforrásokat, és az alhálózatok majd alkalmazza az NSG-k. A szándéka az, hogy a hálózati elkülönítés, amely el megfelelően konfigurálása réteg létrehozása a [hálózati biztonság](../best-practices-network-security.md) képességek az Azure-ban.

Is használhatja a just-in-time (JIT) VM-hozzáférési szolgáltatást az Azure Security Center annak vezérléséhez, aki hozzáférhet távoli egy adott virtuális géphez, és mennyi ideig.

A szervezeteknek, amelyek nem kényszerítenek hálózati-hozzáférés korlátozása az Internet felé néző virtuális gépek biztonsági kockázatok, például a távoli asztal protokoll (RDP) találgatásos támadással érhetők el.

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Inaktív adatok a virtuális gépek a titkosítási kényszerítésével védelme

[Inaktív adatok titkosítása](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés adatvédelmi, megfelelőségi és az adatok közös joghatóság alá felé. [Az Azure Disk Encryption](../security/azure-security-disk-encryption.md) lehetővé teszi a rendszergazdák számára a Windows és Linux rendszerű infrastruktúra-szolgáltatási virtuális lemezek titkosításához. Lemeztitkosítás egyesíti a szabványos Windows BitLocker és a Linux dm-crypt program segítségével kötettitkosítást az operációs rendszer és az adatlemezek biztosít.

Adatok titkosítása és megakadályozhatja az adatok a szervezeti biztonsági és megfelelőségi követelmények teljesítéséhez is alkalmazhatja. A szervezet érdemes megfontolni titkosítással segítségével mérsékelhetik a kockázatokat jogosulatlan kapcsolatos adatok elérése. Javasoljuk továbbá, a meghajtók titkosítja azokat a bizalmas adatok írása előtt.

Győződjön meg arról, a virtuális gép az adatkötetek a védelmükhöz, az Azure storage-fiók aktívan titkosításához. A titkosítási kulcsok és titkos kulcs védelme használatával [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/).

A szervezetek, amelyeket nem az adattitkosítás több adatintegritás problémák vannak kitéve. Például az illetéktelen vagy rosszindulatú felhasználók előfordulhat, hogy sérült biztonságú fiók adatok ellopására vagy ClearFormat a kódolt adatok jogosulatlan elérésére. Mellett véve ilyen kockázatokról, iparági szabályozások ahhoz, hogy a vállalatok igazolja, hogy gondossággal gyakorló, és megfelelő biztonsági vezérlők segítségével javítható az adatok biztonsági.

Lemeztitkosítás kapcsolatos további információkért lásd: [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>A virtuális gép frissítések kezelése

Azure virtuális gépeken, például a helyszíni virtuális gépek összes, az célja, hogy lehet a felhasználó által felügyelt, mert az Azure Windows-frissítések nem rájuk leküldéses. Áll, azonban javasolt, hogy hagyja meg az automatikus Windows Update-beállítás engedélyezve van. Egy másik lehetőség egy központi telepítése [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) vagy egy másik virtuális gép vagy a helyszíni egy másik megfelelő frissítés-felügyeleti termék. A WSUS és a Windows Update virtuális gépek naprakészen tartása. Azt javasoljuk, hogy beolvasási termék segítségével ellenőrizze, hogy az infrastruktúra-szolgáltatási virtuális gépeket naprakészek legyenek.

Azure által biztosított készlet lemezképek rendszeresen frissülnek a Windows-frissítések utolsó ciklikus tartalmazza. Van azonban nem garantálja, hogy a képek a központi telepítéskor aktuális lesz. Lehet, hogy némi késés (legfeljebb néhány héttel) a következő nyilvános kiadásokban lehetséges. Ellenőrzése és az összes Windows-frissítések telepítése az első lépés minden üzembe helyezés kell lennie. Ez a mérték különösen fontos alkalmazza, ha Ön vagy a saját könyvtár érkező lemezképek központi telepítése. Az Azure piactéren részeként biztosított lemezképet alapértelmezés szerint automatikusan frissülnek.

A szervezeteknek, amelyek nem a szoftverfrissítés házirendek kényszerítésére szolgáló ismert, korábban rögzített réseit több érhetők el. Mellett, iparági szabályozások ahhoz, hogy ilyen veszélyek kockázata vállalatok igazolja, hogy gondossággal gyakorló, és megfelelő biztonsági vezérlők segítségével az alkalmazások és szolgáltatások a felhőben található biztonságát segítik elő.

Fontos, hogy hangsúlyozzák, szoftverfrissítési gyakorlati tanácsok a hagyományos adatközpontok és a Azure IaaS sok a hasonlóság rendelkezik-e. Ezért azt javasoljuk, hogy a jelenlegi frissítési házirendjeit, hogy tartalmazza a virtuális gépek értékeli.

## <a name="manage-your-vm-security-posture"></a>A virtuális gép biztonságot kezelése

Számítógépes fenyegetések vannak fejlődnek, és a gazdag ellenőrzési képesség, amely gyorsan észleli a veszélyeket, jogosulatlan hozzáférés elkerülése érdekében az erőforrások, aktiváltak riasztásokat, és vakriasztások számának csökkentése érdekében a virtuális gépek védelmére van szükség. Ilyen egy munkaterhelés számára meghatározott biztonsági előírások minden biztonsági szempontból a virtuális Gépet, a hálózati hozzáférés felügyelete magában foglalja.

A biztonságot a figyelheti a [Windows](../security-center/security-center-virtual-machine.md) és [Linux virtuális gépek](../security-center/security-center-linux-virtual-machine.md), használjon [az Azure Security Center](../security-center/security-center-intro.md). Az Azure Security Centerben megakadályozhatja a virtuális gépek alapul véve a következő lehetőségeket:

* Az ajánlott konfiguráció szabályok az operációs rendszer biztonsági beállításainak alkalmazása
* Azonosíthatja, és töltse le a rendszer biztonsági és kritikus frissítések hiányoznak
* Végpont kártevőirtó védelmi javaslatok telepítése
* Ellenőrizze a lemez titkosítása
* Mérheti fel, és biztonsági rések javítása
* Fenyegetésészlelés

A Security Center aktívan képes figyelni a fenyegetések, és a potenciális fenyegetések ki vannak téve a **biztonsági riasztások**. Korrelált fenyegetések összesítése nevű egyetlen nézetben **biztonsági incidens**.

Ha szeretné megtudni, hogyan Security Center segítenek azonosítani azokat a virtuális gépek Azure-ban található a potenciális fenyegetések, tekintse meg a következő videót:

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

A szervezeteknek, amelyek nem kényszerítenek az erős biztonságot, a virtuális gépek továbbra is tudnak a jogosulatlan felhasználók lehetséges tett kísérleteknek meghatározott biztonsági vezérlők.

## <a name="monitor-vm-performance"></a>Virtuális gép teljesítményének figyelése

Erőforrás visszaélések problémát okozhat, ha a virtuális gép folyamat több erőforrást, mint azok kell. Teljesítménnyel kapcsolatos problémák, a virtuális gép sérti a rendszerbiztonsági tagot rendelkezésre állási szolgáltatás megszűnésének vezethet. Ezért rendkívül fontos VM hozzáférés nem csak reaktív figyelésére, amíg egy probléma jelentkezett, de is proaktív alapteljesítményének a normál működés során mért ellen.

Elemzésével [Azure diagnosztikai naplófájlok](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), figyelheti a Virtuálisgép-erőforrások, és, mely veszélyeztetheti a teljesítmény és rendelkezésre állás lehetséges problémák azonosítása. Az Azure Diagnostics bővítmény figyelése és diagnosztikai képességeket biztosít a Windows-alapú virtuális gépeken. Ezek a képességek a kiterjesztéssel együtt részeként engedélyezheti a [Azure Resource Manager sablon](../virtual-machines/windows/extensions-diagnostics-template.md).

Is [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-metrics.md) címinfrastruktúra megjelenítési lehetőségeinek az erőforrás állapotát.

A szervezeteknek, amelyek nem a virtuális gép teljesítményének figyelése nem határozható meg, hogy bizonyos teljesítmény változására-e a normál és rendellenes. A virtuális gép nem használ-e további erőforrások, mint a normál, ha egy ilyen anomáliadetektálási oka lehet egy potenciális támadási egy külső erőforrás vagy sérült biztonságú a virtuális gép futó folyamat.
