---
title: Azure Automation - Azure Key Vault használatával az Azure Key Vault kezelése |} A Microsoft Docs
description: Ismerje meg hogyan az Azure Automation szolgáltatást is használható az Azure Key Vault kezeléséhez.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: ace32968808dfa919e6ca5d5777818d2672249fe
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224873"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Azure Automation használatával az Azure Key Vault felügyelete

Ez az útmutató vezet be, az Azure Automation szolgáltatást, és hogyan használható a kulcsok és titkos kulcsok Azure Key vaultban kezelésének egyszerűsítéséhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?

[Az Azure Automation](../automation/automation-intro.md) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása és a kívánt állapot konfigurációs az Azure-szolgáltatások. Az Azure Automationnel, manuális, ismétlődő, hosszú futású, és sok hibalehetőséget rejtő feladat automatizálható a megbízhatóság, hatékonyságot és a szervezet teljesítések idejét.

Az Azure Automation segítségével egy rendkívül megbízható, magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely igény szerint méretezhető. Az Azure Automation szolgáltatás folyamatokat is lehet kezdődjön manuálisan, 3. rendszerekben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai és a fejlesztési és üzemeltetési csapatának, amely üzleti értéket hozzáadja a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó váltással munkára.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Hogyan segít az Azure Automation kezelése az Azure Key Vault?

A Key Vault segítségével kezelhetők az Azure Automationben a [AzureRM Key Vault-parancsmagok](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) és [Azure klasszikus Key Vault-parancsmagok](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Az Azure-modul klasszikus Key Vault kezeléséhez érhető el automatikusan az Azure Automationben, és importálhatja a [AzureRM-KeyVault modul](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) az Azure Automationbe, így számos, a Key Vault-felügyeleti feladatokat hajthat végre a szolgáltatásban. A modul importálása az Azure Automationbe kapcsolatos további információkért lásd: [kezelése az Azure Automationben modulokkal](../automation/shared-resources/modules.md) is állítja párba ezeket a parancsmagokat az Azure Automation parancsmagjaival más Azure-szolgáltatások, között összetett feladatok automatizálása Azure-szolgáltatások és a 3. fél rendszerek.

Az Azure Key Vault parancsmagokban végezheti el ezeket a feladatokat, többek között: 

* Hozzon létre, és a egy key vault beállítása
* Hozzon létre vagy importáljon egy kulcs
* Hozzon létre vagy titkos kulcs frissítése
* A kulcs attribútumok frissítése
* Egy kulcs vagy titkos kód beolvasása
* Egy kulcs vagy titkos kód törlése

Íme néhány példa a PowerShell használatával a Key Vault kezeléséhez:  

* [Az Azure Key Vault - lépésről lépésre](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [És egy Azure Key Vault konfigurálása](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az alapokat, az Azure Automationben, és hogyan használható az Azure Key Vault kezeléséhez, az alábbi hivatkozásokból tudhat meg többet az Azure Automationben.

* Tekintse meg az Azure Automation [kezdeti lépéseket ismertető oktatóanyag](../automation/automation-first-runbook-graphical.md).
* Tekintse meg a [Azure Key Vault PowerShell-parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

