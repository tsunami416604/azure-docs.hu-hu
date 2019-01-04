---
title: Az Azure-SSIS integrációs modul licencelt összetevőinek telepítése |} A Microsoft Docs
description: Megtudhatja, hogyan fejleszthet a független Szoftverszállító, és telepítse a fizetős verzióra, vagy egyéni összetevők az Azure-SSIS integrációs modul licencelt
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022325"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul egyéni fizetős vagy licencelt összetevőinek telepítése

Ez a cikk bemutatja, hogyan fejlesztheti és az Azure-SSIS integrációs modul az Azure-ban futó SQL Server Integration Services (SSIS) csomagjainak fizetős vagy licenccel rendelkező egyéni összetevők telepítéséhez egy független.

## <a name="the-problem"></a>A probléma

Az Azure-SSIS integrációs modul jellege több esetleges problémát, amely az egyéni összetevők nem megfelelő a helyszíni telepítéshez használható tipikus licencelési módszerek mutat be. Ennek eredményeképpen az Azure-SSIS integrációs modul eltérő megközelítést igényel.

-   Az Azure-SSIS integrációs modul csomópontján felejtő és lefoglalt vagy bármikor nyilvánosan. Például elindíthatja, vagy állítsa le a csomópontokat a költségek kezelése és skálázható felfelé és lefelé a különböző csomópontméretek keresztül. Ennek eredményeképpen egy adott csomópont egy külső összetevő licenc kötése gépspecifikus adatai, például a MAC-cím vagy a CPU-azonosítója használatával már nem működőképes.

-   Az Azure-SSIS integrációs modul vagy, hogy a csomópontok számát is csökkentheti, vagy bontsa ki a tetszőleges időpontban is méretezheti.

## <a name="the-solution"></a>A megoldás

Az előző szakaszban leírt hagyományos licencelési módszerek vonatkozó korlátozások miatt az Azure-SSIS integrációs modul új megoldást kínál. Ez a megoldás a Windows környezeti változók és SSIS rendszerváltozók vazba licence és külső összetevők érvényesítése használ. ISV-k segítségével ezeket a változókat egyedi és a perzisztens adatok beszerzése egy Azure-SSIS integrációs modul, például Fürtazonosító és a fürtcsomópontok számának. Ezt az információt, az ISV-k majd a licenc az összetevő kötést hozhasson létre egy Azure-SSIS integrációs modul *fürtként*. A kötés használja egy Azonosítót, amely az ügyfelek indítása vagy leállítása, -vagy leskálázhatja, méretezési és leskálázása, vagy konfigurálja újra az Azure-SSIS integrációs modul semmilyen módon nem változik.

Az alábbi ábrán látható, a szokásos telepítéshez, aktiválási és vazba licence, és érvényesítési folyamatok külső összetevők, amelyek ezeket a változókat használják:

![Licencelt összetevőinek telepítése](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Utasítások
1. Független szoftverszállítók számára elérhetővé teheti a licencelt összetevőinek a különböző SKU-k vagy a szintet (például egyetlen csomópont, akár 5 csomópont, akár 10 csomópont, és így tovább). A független Szoftvergyártók biztosít a megfelelő termékkulcsot, amikor a ügyfelek vásárolhat egy termék. A független Szoftvergyártók is lehetővé teszi egy Azure Storage blob-tároló, amely egy ISV-beállítási szkript és a kapcsolódó fájlokat tartalmazza. Ügyfelek ezeket a fájlokat másolja be a saját tárolót, és módosítsa azokat saját termékkulccsal (például a futó `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Az ügyfelek akkor kiépítése, vagy konfigurálja újra az Azure-SSIS integrációs modul paraméterként a tároló SAS URI-azonosítójú. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Az Azure-SSIS integrációs modul van kiépítve, vagy konfigurálja újra, ha az ISV-telepítő lekérdezni a Windows környezeti változók minden csomópontján fut-e `SSIS_CLUSTERID` és `SSIS_CLUSTERNODECOUNT`. Az Azure-SSIS integrációs modul majd aktiválási kulcs létrehozásához a fürt Azonosítójával és a termékkulcsot a licencelt terméket a ISV-aktiválási kiszolgálóra küldi el.

3. Az aktiválási kulcs megjelenését követően ISV telepítő tárolhatja a kulcs helyben (például a beállításjegyzékben) minden egyes csomóponton.

4. Ha ügyfeleink egy csomagot, amely az ISV-licenccel rendelkező összetevőt használja az Azure-SSIS integrációs modul csomópontján futtatja, a csomag beolvassa a helyileg tárolt aktiválási kulcsot, és érvényesíti, szemben a csomópont fürt azonosítója. A csomag is jelentheti a fürtcsomópontok számának az ISV-aktiválási kiszolgálón.

    Íme egy példa, amely érvényesíti a termékaktiválási kulcsot, és a fürtcsomópontok számának jelentések kódra:

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

## <a name="isv-partners"></a>ISV-partnereink

Az ISV-partnereknek, akik az az Azure-SSIS integrációs modul ebben a blogbejegyzésben - végén rendelkezik az összetevők és bővítmények igazítani listáját találja [Enterprise Edition, egyéni beállításai, és 3. fél bővíthetőségi ADF az SSIS-hez](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Az Azure-SSIS integrációs modul Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
