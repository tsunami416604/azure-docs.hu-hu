---
title: Az Azure-SSIS-integrációs futásidejű licencelt összetevők telepítése |} Microsoft Docs
description: Megtudhatja, hogyan fejleszthet független, és telepítse a fizetős, vagy az Azure-SSIS-integrációs futásidejű egyéni összetevők licence
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 146dc8c4475a041f28d7fe7ca464dfbc104258c7
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265954"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS-integrációs futásidejű fizetett vagy licencelt egyéni összetevők telepítéséhez

Ez a cikk ismerteti, hogyan független fejlesztése és az Azure-SSIS-integrációs futásidejű az Azure-ban futó SQL Server Integration Services (SSIS) csomagok fizetett vagy licencelt egyéni összetevők telepítéséhez.

## <a name="the-problem"></a>A probléma

Az Azure-SSIS-integrációs futásidejű jellege számos kihívást, amelyek révén a tipikus szoftverlicencelési eljárásokkal egyéni összetevők nem megfelelő a helyszíni telepítéshez használt mutatja be. Az Azure-SSIS infravörös emiatt másik módszert igényel.

-   A csomópontok az Azure-SSIS infravörös "volatile" és a lefoglalva vagy bármikor kiadott. Például elindításához, vagy állítsa le a csomópontok költségeinek kezelése vagy felfelé és lefelé méretezési különböző csomópont méretű keresztül. Emiatt egy külső összetevő licenc kötése egy adott csomópont a számítógép-specifikus adatokkal, például a MAC-cím vagy a CPU-azonosító már nem működőképes.

-   Az Azure-SSIS-IR vagy, hogy a csomópontok száma is párbeszédpanelként bármikor is méretezheti.

## <a name="the-solution"></a>A megoldás

Az előző szakaszban leírt hagyományos szoftverlicencelési eljárásokkal korlátai miatt az Azure-SSIS infravörös új megoldást kínál. Ez a megoldás a Windows környezeti változók és SSIS rendszerváltozók a licenc kötése és a külső gyártótól származó összetevőkhöz érvényesítése használ. ISV-k segítségével változókhoz egyedi és állandó adatait az Azure-SSIS-IR, például a fürt azonosítója és a fürt csomópontok száma. Ezeket az adatokat az ISV-k majd köthető a licenc, az összetevő egy Azure-SSIS-IR *fürtként*. A kötés egy Azonosítót, amely nem változik, az ügyfelek elindításakor vagy állítsa le, méretezhető felfelé vagy lefelé méretezési bejövő vagy kimenő vagy konfigurálja újra az Azure-SSIS infravörös bármilyen módon használja.

Az alábbi ábrán látható, a szokásos telepítési, az aktiválás és a licenc kötése, és új változókhoz használó külső összetevők forgalomáramlás érvényesítési:

![A licenccel rendelkező összetevők telepítéséhez](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Utasítások
1. ISV-k kínálhat a licencelt összetevők különböző termékváltozatok vagy rétegek (például egyetlen csomópont, akár 5 csomópontok, legfeljebb 10 csomópontokat, és így tovább). Az ISV biztosít a megfelelő termékkulcs, amikor az ügyfelek a termék beszerzésére. Az ISV is biztosít az Azure blob tároló, amely egy ISV telepítési parancsfájlt és a kapcsolódó fájlokat tartalmaz. Ügyfelek ezek a fájlok másolása a saját tárolót, valamint módosíthatók a saját termékkulccsal (például futtatásával `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Az ügyfelek akkor kiépítése, vagy konfigurálja újra a paraméterként a tároló SAS URI-azonosítójú Azure-SSIS-IR. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

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

## <a name="isv-partners"></a>ISV partnerek

Az ISV-partnereknek, amelyek leírtakon az összetevők és a bővítmények az ebben a blogbejegyzésben - végén az Azure-SSIS-IR listáját megtalálja [Enterprise Edition, az egyéni telepítés és a 3. fél bővíthetőséget az ADF SSIS](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS-integrációs futásidejű egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS integrációs futásidejű Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
