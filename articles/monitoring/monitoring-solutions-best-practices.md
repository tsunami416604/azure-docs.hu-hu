---
title: Gyakorlati tanácsok az Azure felügyeleti megoldás |} Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 0bd5e19e00dbae1d0ece27d0498a1f599dba05b7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887838"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Ajánlott eljárások megoldások létrehozásához Azure (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum megoldások létrehozásához Azure, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.  

Ez a cikk vonatkozó gyakorlati tanácsokat [felügyeleti megoldás fájl létrehozásával](monitoring-solutions-solution-file.md) az Azure-ban.  Ezek az információk további gyakorlati tanácsok meghatározott frissül.

## <a name="data-sources"></a>Adatforrások
- Adatforrások lehet [egy Resource Manager sablonhoz konfigurált](../log-analytics/log-analytics-template-workspace-configuration.md), de azok nem tartozhatnak a megoldásfájlt.  Az oka, hogy az adatforrások konfigurálása jelenleg nem idempotent, ami azt jelenti, hogy a megoldás felülírhatja a meglévő konfigurációt a felhasználó munkaterületen.<br><br>A megoldás például szükség lehet az alkalmazások eseménynaplójában keresse meg a figyelmeztetési és események.  Ha megadja ezt adatforrásként a megoldásban, azzal kockáztatja eltávolítása tájékoztató eseményeket, ha a felhasználó rendelkezik-e ez nincs konfigurálva a munkaterületen.  Szereplő összes esemény, ha majd, előfordulhat, hogy lehet túlzott információk események gyűjtése a felhasználó munkaterületen.

- Ha a megoldás a szabványos adatok forrásokból származó adatok szükséges, majd be kell állítania a előfeltételként.  Állapot dokumentáció, hogy az ügyfél önállóan kell konfigurálja az adatforrást.  
- Adja hozzá a [az egymást követő ellenőrzése](../log-analytics/log-analytics-view-designer-tiles.md) arra utasítani a felhasználónál az adatforrásokat, úgy kell konfigurálni a szükséges adatok összegyűjtésére a megoldásban nézetekhez üzenet.  Ez az üzenet jelenik meg a nézet a csempe amikor szükséges adatok nem található.


## <a name="runbooks"></a>Runbookok
- Adja hozzá egy [automatizálás ütemezést](../automation/automation-schedules.md) minden runbook a megoldásban, amelyet a fussanak ütemezés szerint.
- Tartalmazza a [IngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) írás a Naplóelemzési összetevőtárházat runbookok által használható a megoldásban.  Konfigurálja a megoldást a [hivatkozás](monitoring-solutions-solution-file.md#solution-resource) úgy, hogy az informatikai marad, ha a rendszer eltávolítja a megoldás ehhez az erőforráshoz.  Ez lehetővé teszi, hogy a modul megosztása több megoldások.
- Használjon [automatizálási változók](../automation/automation-schedules.md) adni a megoldás, amely a felhasználók később módosítani szeretné az értékeket.  Akkor is, ha a változó tartalmaz a megoldás van konfigurálva, az érték továbbra is módosítható.

## <a name="views"></a>Nézetek
- Minden megoldás tartalmaznia kell a felhasználói portál alkalmazásban megjelenő egyetlen nézetben.  A nézet több tartalmazhat [képi megjelenítés részek](../log-analytics/log-analytics-view-designer-parts.md) mutatja be az adatok különböző csoportjai számára.
- Adja hozzá a [az egymást követő ellenőrzése](../log-analytics/log-analytics-view-designer-tiles.md) arra utasítani a felhasználónál az adatforrásokat, úgy kell konfigurálni a szükséges adatok összegyűjtésére a megoldásban nézetekhez üzenet.
- Konfigurálja a megoldást a [tartalmazhat](monitoring-solutions-solution-file.md#solution-resource) úgy, hogy az informatikai tartozó eltávolítja, ha a megoldás a rendszer eltávolítja a nézetet.

## <a name="alerts"></a>Riasztások
- A címzettek listáját határozza meg a megoldásfájlt paraméterként, a felhasználó megadhatja őket, amikor a megoldás telepítése.
- Konfigurálja a megoldást a [hivatkozás](monitoring-solutions-solution-file.md#solution-resource) riasztás szabályok úgy, hogy a felhasználók módosíthatják a konfigurációt.  Előfordulhat, hogy szeretnék módosítani a címzettek listájának módosítása, a riasztás küszöbértéke módosítása vagy a riasztási szabály letiltása. 


## <a name="next-steps"></a>További lépések
* Végezze el az alapvető folyamatainak [tervezéséről és kiépítéséről olyan felügyeleti megoldást](monitoring-solutions-creating.md).
* Megtudhatja, hogyan [hozzon létre egy megoldást fájlt](monitoring-solutions-solution-file.md).
* [Adja hozzá a mentett keresések és riasztások](monitoring-solutions-resources-searches-alerts.md) a kezelési megoldással.
* [Nézetek hozzáadása](monitoring-solutions-resources-views.md) a kezelési megoldással.
* [Adja hozzá az Automation-forgatókönyveket és egyéb erőforrások](monitoring-solutions-resources-automation.md) a kezelési megoldással.

