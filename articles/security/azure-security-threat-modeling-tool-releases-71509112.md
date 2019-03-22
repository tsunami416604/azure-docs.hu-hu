---
title: A Threat Modeling Tool kiadásai – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: Dokumentálja a threat modeling tool kibocsátási megjegyzései
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890916"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA kiadás 7.1.50911.2 – 9/12/2018.

Örömmel jelentjük be, a Microsoft Threat Modeling Tool már elérhető egy támogatott általánosan elérhető (GA) verzió-ként való letöltéséhez. Ez a kiadás tartalmazza a fontos adatvédelmi és biztonsági frissítések, valamint hibajavításokat tartalmaz, a szolgáltatásfrissítések és stabilitási fejlesztések. A 2017 előzetes verziójának meglévő felhasználók frissítése a legújabb kiadásra a ClickOnce technológiával megnyitásakor az ügyfél kéri. Az új felhasználók számára az eszköz [Ide kattintva letöltheti az ügyfél](https://aka.ms/threatmodelingtool).

Ebben a kiadásban azt befejezése esetén a 2017 előzetes verziójának támogatása és az előzetes verziójának frissítése az összes felhasználó ajánlaná a GA kiadás. Vagy későbbi 2018. október 15-as a Threat Modeling Tool for ClickOnce minimálisan szükséges verzió állítjuk be, és minden előzetes ügyfél frissítése szükséges.

A Microsoft Threat Modeling eszköz 2016, érhető el a a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), csak a kritikus fontosságú biztonsági javítások esetében támogatott. október 1 2019-ig marad.

## <a name="feature-changes"></a>A szolgáltatás módosítások

### <a name="azure-stencil-updates"></a>Az Azure rajzsablonon frissítések

További Azure-rajzsablonokhoz és azok kapcsolódó fenyegetések és megoldást a állítsa ezzel a kiadással szállítási rajzsablonon lettek hozzáadva. Jelentős változások történtek a fókuszterületek "Azure App Services", "Azure-adatbázis ajánlatokat" és "Az Azure Storage."

![Az Azure rajzsablonon frissítések](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Eltávolítja a onedrive vállalati verzió integrációs funkciója

Az előzetes verzió "Mentése a onedrive-ra", "Nyissa meg a onedrive vállalati verzió" és "Egy hivatkozás megosztása" funkciók el lettek távolítva. A felhasználók onedrive vállalati verzió, javasolt, hogy használja a Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) ügyfél számára a hozzáférést a keresztül a standard szintű fájl mentése a onedrive vállalati verzióban tárolt fájlokhoz, és nyissa meg a párbeszédpanelek útmutatásait.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Hibajavítás változóé ügyfelei által jelentett

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>TMT előzetes verzióban elérhető az eszköz összeomlik, ha a standard sablon használatával

- Egy általános rajzsablonon (például "általános adatfolyam") adnak hozzá a grafikus felület és fenyegetések állít elő, amikor az eszköz összeomolhat. Ez a probléma megoldódott.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>TMT előzetes verzióban elérhető a mentett jelentés vagy a fenyegetések, másolja a kockázati szintek helytelenek

- Ha a felhasználó módosítja az egyes veszélyforrások kockázati szint, és ezután menti a jelentést vagy másolja át a kockázatokat, visszaállíthatja a kockázati szint "Nagy". Ez a probléma megoldódott.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Felhasználók, nagy felbontású képernyők tapasztalhatnak a kis méretű szöveges threat tulajdonságai

#### <a name="issue"></a>Probléma

Az eszköz elemző nézetben, ha a felhasználónak a nagy felbontású képernyő, amely alapértelmezés szerint az olvashatóság érdekében a Windows, magnify fenyegetést "Lehetséges Mitigation(s)" szakaszában jelenhetnek meg kis méretű szöveges.

![A nagy felbontású képernyők ismert problémái](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Áthidaló megoldás

Kattintson a kockázatcsökkentési szöveg és a szabványos Windows nagyítási vezérlő (CTRL-egér kerekének mentése) használja, ez a szakasz a nagyítás növeléséhez a felhasználónak.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Előfordulhat, hogy a fő ablakot "Legutóbb megnyitott modellek" szakaszában fájlok nem nyílik meg

#### <a name="issue"></a>Probléma

Az előzetes kiadásban "Nyissa meg a onedrive vállalati verzió" funkcióját el lett távolítva. "Legutóbb megnyitott modellek" a onedrive-ra mentett rendelkező felhasználók a következő hibaüzenetet fog kapni.

![Onedrive vállalati verzió-funkció eltávolítása](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Áthidaló megoldás

A felhasználók onedrive vállalati verzió, javasolt, hogy használja a Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) ügyfél számára hozzáférést a standard és a "Nyissa meg a modell" párbeszédpanelen keresztül onedrive vállalati verzióban tárolt fájlokhoz.

![Onedrive vállalati verzió-funkció eltávolítása](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Saját szervezet az eszköz 2016-os verzióját használja, használható az Azure rajzsablonon set?

Igen, akkor is! A [Azure rajzsablonon beállítása a githubon érhető el](https://github.com/Microsoft/threat-modeling-templates/), és betölti a 2016-os verzióját az eszköz lehet. Új modell létrehozása az Azure rajzsablonon együtt, használja a főmenü képernyőn a "Sablon az új modellek" párbeszédpanelen. TMT 2016 nem jelennek meg a "Lehetséges megoldások" mezőket az Azure rajzsablonon készlet található hivatkozásokat, így láthatja a hivatkozások HTML-címkék jelennek meg.

![Az Azure rajzsablonon frissítések 2016-ügyfél](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - Microsoft Windows 10
- .NET-verzió megadása kötelező
  - .NET 3.5.2
- További követelmények
  - Internetkapcsolat szükséges az eszköz, valamint a sablonok a frissítések fogadásához.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációjában található [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), és tartalmaz adatokat [az eszközzel kapcsolatos](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>További lépések

Töltse le a legújabb verzióját a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
