---
title: Licencelt összetevők telepítése az Azure-SSIS Integration Runtime-hoz | Microsoft Docs
description: Ismerje meg, hogy az ISV hogyan fejleszthet és telepíthet fizetős vagy licencelt egyéni összetevőket az Azure-SSIS Integration Runtime számára
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1c574578e6ed6ee032be01718eb3e8afd27fdf6f
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708009"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul fizetős vagy licencelt egyéni összetevőinek telepítése

Ez a cikk azt ismerteti, hogy egy ISV hogyan fejleszthet és telepíthet fizetős vagy licencelt egyéni összetevőket az Azure-ban futó SQL Server Integration Services-(SSIS-) csomagok számára az Azure-SSIS Integration Runtime-ban.

## <a name="the-problem"></a>A probléma

Az Azure-SSIS integrációs modul természete számos kihívást jelent, amelyek az egyéni összetevők helyszíni telepítésének szokásos licencelési módszereit nem kielégítően használják. Ennek eredményeképpen az Azure-SSIS IR-nek más megközelítésre van szüksége.

-   Az Azure-SSIS integrációs modul csomópontjai változékonyak, és bármikor lefoglalhatók vagy közzétehetők. Például elindíthatja vagy leállíthatja a csomópontokat a díjak kezeléséhez, illetve a különböző csomópontok méretének méretezésével. Ennek eredményeképpen a külső gyártótól származó licencek egy adott csomóponthoz való kötése a számítógép-specifikus adatok, például a MAC-címek vagy a CPU-AZONOSÍTÓk használatával már nem valósítható meg.

-   Az Azure-SSIS integrációs modult is méretezheti vagy kicsinyítheti, hogy a csomópontok száma bármikor csökkenhet vagy kibontható legyen.

## <a name="the-solution"></a>A megoldás

Az előző szakaszban ismertetett hagyományos licencelési módszerek korlátai miatt az Azure-SSIS IR új megoldást biztosít. Ez a megoldás a Windows környezeti változóit és SSIS rendszerváltozókat használ a licencek kötéséhez és a külső összetevők érvényesítéséhez. A független szoftvergyártók ezeket a változókat használva egyedi és állandó adatokat szerezhetnek be egy Azure-SSIS IR-hez, például a fürt AZONOSÍTÓját és a fürtcsomópontok darabszámát. Ezzel az információval a független szoftvergyártók ezt követően *egy*Azure-SSIS integrációs modult használhatnak fürtként. Ez a kötés olyan azonosítót használ, amely nem változik az ügyfelek indításakor vagy leállításakor, vertikális fel-vagy leskálázással, méretezéssel vagy kicsinyítéssel, illetve az Azure-SSIS IR bármilyen módon történő újrakonfigurálásával.

Az alábbi ábrán az új változókat használó, harmadik féltől származó összetevőkre jellemző telepítési, aktiválási és licenc-kötési és érvényesítési folyamatok láthatók:

![Licencelt összetevők telepítése](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Útmutatás
1. A független szoftvergyártók a licencelt összetevőiket különböző SKU-ban vagy rétegekben (például egyetlen csomópont, legfeljebb 5 csomópont, legfeljebb 10 csomópont stb.) használhatják. Az ISV biztosítja a megfelelő termékkulcsot, amikor az ügyfelek vásárolnak egy terméket. Az ISV olyan Azure Storage BLOB-tárolót is biztosíthat, amely egy ISV telepítési parancsfájlt és egy kapcsolódó fájlt tartalmaz. Az ügyfelek átmásolhatják ezeket a fájlokat a saját tárolóba, és módosíthatják azokat a saját termékkulcs használatával (például a `IsvSetup.exe -pid xxxx-xxxx-xxxx`futtatásával). Az ügyfelek ezt követően az Azure-SSIS IR-t a tárolójuk SAS URI-ja paraméterként lehet kiépíteni vagy újrakonfigurálni. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Ha az Azure-SSIS IR-t kiépítik vagy újrakonfigurálták, az ISV-telepítő minden csomóponton fut, hogy lekérdezze `SSIS_CLUSTERID` a `SSIS_CLUSTERNODECOUNT`Windows környezeti változóit, és. Ezután az Azure-SSIS IR elküldi a fürt AZONOSÍTÓját és a licencelt termék termékkulcsot az ISV aktiválási kiszolgálónak egy aktiválási kulcs létrehozásához.

3. Az aktiválási kulcs kézhezvétele után az ISV telepítő helyileg képes tárolni a kulcsot az egyes csomópontokon (például a beállításjegyzékben).

4. Amikor az ügyfelek egy olyan csomagot futtatnak, amely az ISV licenccel rendelkező összetevőjét használja az Azure-SSIS IR csomópontján, a csomag beolvassa a helyileg tárolt aktiválási kulcsot, és érvényesíti a csomópont fürt-azonosítójával. A csomag a fürtcsomópontok számának megadását is lehetővé teszi az ISV-aktiválási kiszolgáló számára.

    Íme egy példa az aktiválási kulcsot érvényesítő kódra, amely a fürtcsomópontok számát jelenti:

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

## <a name="isv-partners"></a>ISV-partnerek

Megtalálhatja azon ISV-partnerek listáját, akik az Azure-SSIS IR-hez tartozó összetevőket és bővítményeket kiigazították a blogbejegyzés végén [, az egyéni telepítés és a harmadik féltől származó BŐVÍTHETŐSÉG SSIS az ADF-ben](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS Integration Runtime egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Az Azure-SSIS Enterprise kiadása Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
