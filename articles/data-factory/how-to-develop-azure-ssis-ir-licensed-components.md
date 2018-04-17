---
title: Az Azure-SSIS-integrációs futásidejű fizetett vagy licencelt összetevőket fejlesztése |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan független fejleszthet, és telepítse a fizetős, vagy az Azure-SSIS-integrációs futásidejű egyéni összetevők licence
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS-integrációs futásidejű fizetett vagy licencelt egyéni összetevők fejlesztése

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Hiba – az Azure-SSIS-IR szükséges másik módszert

Az Azure-SSIS-integrációs futásidejű jellege számos kihívást, amelyek révén a tipikus szoftverlicencelési eljárásokkal egyéni összetevők nem megfelelő a helyszíni telepítéshez használt mutatja be.

-   A csomópontok az Azure-SSIS infravörös "volatile" és a lefoglalva vagy bármikor kiadott. Például elindításához, vagy állítsa le a csomópontok költségeinek kezelése vagy felfelé és lefelé méretezési különböző csomópont méretű keresztül. Emiatt egy külső összetevő licenc kötése egy adott csomópont a számítógép-specifikus adatokkal, például a MAC-cím vagy a CPU-azonosító már nem működőképes.

-   Az Azure-SSIS-IR vagy, hogy a csomópontok száma is párbeszédpanelként bármikor is méretezheti.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Megoldás - Windows környezeti változók és SSIS rendszerváltozók licenc kötése és érvényesítése

Az előző szakaszban leírt hagyományos szoftverlicencelési eljárásokkal vonatkozó korlátozások miatt az Azure-SSIS infravörös biztosít a Windows környezeti változók és SSIS rendszerváltozók a licenc kötése és a külső összetevők érvényesítése. ISV-k segítségével változókhoz egyedi és állandó adatait az Azure-SSIS-IR, például a fürt azonosítója és a fürt csomópontok száma. Ezeket az adatokat az ISV-k az összetevő a licencet kell kötni az Azure-SSIS-IR *fürtként*, azonosítóval, amely nem változik, ha az ügyfelek indítása és leállítása, vertikális felskálázás le, a vagy horizontális skálázása vagy konfigurálja újra az Azure-SSIS infravörös bármilyen módon.

Az alábbi ábrán látható, a szokásos telepítési, az aktiválás és a licenc kötése, és új változókhoz használó külső összetevők forgalomáramlás érvényesítési:

![A licenccel rendelkező összetevők telepítéséhez](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Utasítások
1. ISV-k kínálhat a licencelt összetevők különböző termékváltozatok vagy rétegek (például egyetlen csomópont, akár 5 csomópontok, legfeljebb 10 csomópontokat, és így tovább). Az ISV biztosít a megfelelő termékkulcs, amikor az ügyfelek a termék beszerzésére. Az ISV is biztosít az Azure blob tároló, amely egy ISV telepítési parancsfájlt és a kapcsolódó fájlokat tartalmaz. Ügyfelek ezek a fájlok másolása a saját tárolót, valamint módosíthatók a saját termékkulccsal (például futtatásával `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Az ügyfelek akkor kiépítése, vagy konfigurálja újra a paraméterként a tároló SAS URI-azonosítójú Azure-SSIS-IR. További információk: [egyéni beállítása az Azure-SSIS-integráció futási időben](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Az Azure-SSIS-IR van üzembe helyezve és újrakonfigurálása, ISV telepítő végrehajtja lekérdezni a Windows környezeti változókat, minden egyes csomóponton `SSIS_CLUSTERID` és `SSIS_CLUSTERNODECOUNT`. Az Azure-SSIS infravörös majd elküldi a fürt Azonosítóval és a termékkulcs a licenccel rendelkező termék az ISV-aktiválási kiszolgálóval az aktiválási kulcs létrehozásához.

3. Azután, hogy megérkezett a termékaktiválási kulcsot, ISV telepítő tárolhatja a kulcs helyben (például a beállításjegyzékben) minden egyes csomóponton.

4. Egy csomagot, amely az ISV licencelt összetevőt használja az Azure-SSIS infravörös csomóponton futtatásakor a felhasználók a csomag beolvassa a helyben tárolt aktiválási kulcs, és érvényesíti azt a csomópont-fürt azonosító ellen A csomag is jelentheti a fürt csomópontok száma az ISV-aktiválási kiszolgálón.

    Íme egy példa, amely ellenőrzi az aktiválási kulcsot jelentést készít a fürt csomópontok száma szabályzat:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS-integrációs futásidejű egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS integrációs futásidejű Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)