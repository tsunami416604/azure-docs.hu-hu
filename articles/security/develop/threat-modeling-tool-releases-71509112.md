---
title: A Microsoft Threat Modeling Tool 2018.
titleSuffix: Azure
description: A fenyegetésmodellező eszköz kiadási megjegyzésekének dokumentálása
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269912"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Fenyegetésmodellező eszköz GA kiadás 7.1.50911.2 - 9/12/2018

Izgatottan várjuk, hogy bejelentsük, hogy a Microsoft Threat Modeling Tool már letölthető, mint egy támogatott általánosan elérhető (GA) kiadás. Ez a kiadás fontos adatvédelmi és biztonsági frissítéseket, valamint hibajavításokat, funkciófrissítéseket és stabilitási fejlesztéseket tartalmaz. A 2017-es előzetes verzió meglévő felhasználóinak az ügyfél megnyitásakor a ClickOnce technológián keresztül frissíteniük kell a legújabb kiadásra. Az eszköz új felhasználói számára [letöltheti az ügyfelet.](https://aka.ms/threatmodelingtool)

Ezzel a kiadással megszüntetjük a 2017-es előzetes verzió támogatását, és javasoljuk az előzetes verzió frissítésének minden felhasználóját a GA-kiadáshoz. 2018. október 15-én vagy azt követően beállítjuk a fenyegetésmodellező eszközhöz szükséges minimális ClickOnce verziót, és minden előzetes verziójú ügyfélnek frissítenie kell.

A Microsoft Threat Modeling Tool 2016, amely elérhető a [Microsoft letöltőközpontban](https://www.microsoft.com/en-us/download/details.aspx?id=49168), továbbra is támogatott, amíg október 1 2019 a kritikus biztonsági javítások csak.

## <a name="feature-changes"></a>Funkcióváltozások

### <a name="azure-stencil-updates"></a>Az Azure stencil frissítései

További Azure-rajzsablonok és a kapcsolódó fenyegetések és megoldások hozzá lettek adva a stencil készlet szállítás a jelen kiadás. Jelentős változások történtek az "Azure App Services", az "Azure Database Offerings" és az "Azure Storage" kiemelt területein.

![Az Azure Stencil frissítései](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>A OneDrive integrációs szolgáltatása eltávolítva

A  eltávolítottuk az előzetes verzió "Mentés OneDrive-ra", "Megnyitás a OneDrive-ról" és "Hivatkozás megosztása" funkcióit. A OneDrive felhasználói nak javasoljuk, hogy a Microsoft [OneDrive for](https://onedrive.live.com/about/en-us/download/) Windows-ügyfélalkalmazássegítségével férjenek hozzá a OneDrive-on tárolt fájljaikhoz a szabványos fájlmentési és megnyitott párbeszédpaneleken keresztül.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Figyelemre méltó fix hibákáltal jelentett ügyfelek

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>A TMT Preview-ban az eszköz összeomlik a szabványos sablon használatakor

- Ha egy általános rajzsablont (például "Általános adatfolyam") ad hozzá a rajzfelülethez, és fenyegetést generál, az eszköz összeomlhat. A probléma ki lett javítva.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>A TMT Preview-ban, amikor egy jelentést mentek vagy lemásolom a fenyegetéseket, a kockázati szintek helytelenek

- Ha a felhasználó módosítja az egyes fenyegetések kockázati szintjét, majd egy jelentést ment vagy lemásolja a kockázatokat, a kockázati szint visszaállhat "Magas" szintre. A probléma ki lett javítva.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>A nagy felbontású képernyők felhasználói kis szöveget tapasztalhatnak a fenyegetéstulajdonságaiban

#### <a name="issue"></a>Probléma

Az eszköz Elemző nézetében, ha a felhasználó nagy felbontású képernyője alapértelmezés szerint a Windows olvashatóságának nagyítására van beállítva, a fenyegetés "Lehetséges kockázatcsökkentés(ek)" szakasza kis szöveggel jelenhet meg.

![Ismert probléma a nagy felbontású képernyőkkel](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Áthidaló megoldás

A felhasználó rákattinthat a segédkező szövegre, és a szabványos Windows zoom vezérlő (Crtl-Mouse Wheel Up) segítségével növelheti az adott szakasz nagyítását.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Előfordulhat, hogy a főablak "Nemrég megnyitott modellek" szakaszában lévő fájlok nem nyílnak meg

#### <a name="issue"></a>Probléma

Az előzetes verzió "Megnyitás a OneDrive-ról" funkciója el lett távolítva. A OneDrive-ra mentett "Nemrég megnyitott modellek" rendelkező felhasználók a következő hibaüzenetet kapják.

![A OneDrive szolgáltatás eltávolítva](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Áthidaló megoldás

A OneDrive felhasználói nak javasoljuk, hogy a Microsoft [OneDrive for](https://onedrive.live.com/about/en-us/download/) Windows-ügyfélalkalmazással férjenek hozzá a OneDrive-on tárolt fájljaikhoz a szokásos és a "Modell megnyitása" párbeszédpanelen keresztül.

![A OneDrive szolgáltatás eltávolítva](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>A szervezetem az eszköz 2016-os verzióját használja, használhatom az Azure stencilkészletet?

Igen, meg teheted! Az [Azure stencil készlet érhető el a GitHubon,](https://github.com/Microsoft/threat-modeling-templates/)és betölthető az eszköz 2016-os verziójában. Ha új modellt szeretne létrehozni az Azure stencil készlettel, használja a "Sablon új modellekhez" párbeszédpanelt a főmenü képernyőn. A TMT 2016 nem tudja megjeleníteni az Azure stencilkészlet "Lehetséges kockázatcsökkentési" mezőiben található hivatkozásokat, ezért a HIVATKOZÁSOK HTML-címkeként jelennek meg.

![Az Azure Stencil frissítései a 2016-os ügyfélben](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - Microsoft Windows 10
- .NET verzió szükséges
  - .NET 3.5.2
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A fenyegetésmodellező eszköz dokumentációja [a docs.microsoft.com](threat-modeling-tool.md)található, és információkat tartalmaz [az eszköz használatával kapcsolatban.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
