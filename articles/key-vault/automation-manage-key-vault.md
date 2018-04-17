---
title: Az Azure Key Vault használatával az Azure Automation kezelése |} Microsoft Docs
description: További tudnivalók hogyan az Azure Automation szolgáltatás használható-e az Azure Key Vault kezeléséhez.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: b5f8a1b826717d51729b0bb621bf26e35d4bdd36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Az Azure Key Vault használatával az Azure Automation kezelése
Az útmutatóból megismerheti az Azure Automation szolgáltatás, és hogyan használható egyszerűbbé teheti a kulcsok és titkos kulcsok Azure Key Vault a kezelését.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation szolgáltatásbeli](../automation/automation-intro.md) van egy Azure-felhő kezelésüket folyamatok automatizálásához és a kívánt állapot konfigurációs szolgáltatás. Használja az Azure Automation, manuális, ismétlődik, hosszan futó és hibalehetőséget feladatok automatizálhatók megbízhatóságát, hatékonyságát és a szervezet hamarabb növelése érdekében.

Azure Automation szolgáltatásbeli biztosít egy magas rendelkezésre állású, nagymértékben megbízható munkafolyamat-végrehajtási motorjának, amely méretezi az igényeinek. Az Azure Automationben folyamatok is lehet kezdődött el manuálisan, a 3. fél rendszerek vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan, ha szükséges.

Csökkentheti a működési munkaterhelés és szabadítson fel informatikai és DevOps alkalmazottak munka, amely üzleti értéket hozzáadja a felhőbeli felügyeleti feladatok automatikusan Azure Automation által futtatandó mozgatásával összpontosíthat.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Hogyan segíthet az Azure Automation kezelése az Azure Key Vault?
Key Vault használatával kezelhető az Azure Automationben a [AzureRM Key Vault parancsmagjainak](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) és [Azure klasszikus Key Vault parancsmagjainak](https://msdn.microsoft.com/library/azure/dn868052.aspx). A klasszikus Key Vault kezeléséhez Azure modul automatikusan az Azure Automationben érhető el, és importálhatja a [AzureRM-KeyVault modul](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) az Azure Automation, hogy a Key Vault felügyeleti feladatok a szolgáltatáson belül végezheti el. Ezek a parancsmagok az Azure Automationben más Azure-szolgáltatások, Azure-szolgáltatások és a 3. fél rendszerek között összetett feladatok automatizálása a parancsmagjaival is párosítható.

Az Azure Key Vault parancsmagjainak hajthatja végre ezeket a feladatokat, többek között: 

* Hozza létre és konfigurálja a kulcstároló
* Hozzon létre vagy kulcs importálása
* Létrehozni vagy frissíteni a titkos kulcs
* Attribútumok kulcs frissítése
* A kulcsok vagy titkos kulcsok beszerzése
* Kulcs vagy titkos kulcs törlése

Íme néhány példa a Key Vault kezelése a PowerShell használatával:  

* [Az Azure Key Vault - lépésről lépésre](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Beállítása és konfigurálása az Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Automation, és hogyan használható az Azure Key Vault kezeléséhez alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Automation.

* Tekintse meg az Azure Automation szolgáltatásbeli [használatába bevezető oktatóanyagot](../automation/automation-first-runbook-graphical.md).
* Tekintse meg a [Azure Key Vault PowerShell-parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

