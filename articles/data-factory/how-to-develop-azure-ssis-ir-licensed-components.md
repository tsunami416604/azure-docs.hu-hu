---
title: Licencelt összetevők telepítése az Azure-SSIS-integrációs futásórához
description: Ismerje meg, hogyan fejleszthet és telepíthet az isv fizetős vagy licencelt egyéni összetevőket az Azure-SSIS-integrációs futásidőhez
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 3a4c1f8b95c66c26944d455a2ab6163d1da72731
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414346"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Fizetős vagy licencelt egyéni összetevők telepítése az Azure-SSIS Integration Runtime-hoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogy az isv hogyan fejleszthet és telepíthet fizetős vagy licencelt egyéni összetevőket az Azure-ban az Azure-ban az Azure-SSIS-integrációs futásidőben futó SQL Server Integration Services (SSIS) csomagokhoz.

## <a name="the-problem"></a>A probléma

Az Azure-SSIS-integrációs futásidő jellege számos kihívást jelent, amelyek az egyéni összetevők helyszíni telepítéséhez használt tipikus licencelési módszereket nem megfelelővé teszik. Ennek eredményeképpen az Azure-SSIS IR más megközelítést igényel.

-   Az Azure-SSIS IR csomópontjai volatilisek, és bármikor lefoglalhatók vagy felszabadíthatók. Például elindíthatja vagy leállíthatja a csomópontokat a költségek kezeléséhez, vagy felfelé és lefelé skálázható a különböző csomópontméreteken keresztül. Ennek eredményeképpen a harmadik féltől származó összetevőlicenc egy adott csomóponthoz kötése gépspecifikus adatok, például MAC-cím vagy CPU-azonosító használatával már nem életképes.

-   Az Azure-SSIS-ir-t is méretezheti be vagy ki, így a csomópontok száma bármikor összezsugorodhat vagy kibővülhet.

## <a name="the-solution"></a>A megoldás

Az előző szakaszban ismertetett hagyományos licencelési módszerek korlátai nak köszönhetően az Azure-SSIS IR új megoldást kínál. Ez a megoldás Windows környezeti változókat és SSIS rendszerváltozókat használ a harmadik féltől származó összetevők licenckötéséhez és érvényesítéséhez. A független szoftverszállítók ezekkel a változókkal egyedi és állandó adatokat kaphatnak egy Azure-SSIS-ir-hez, például fürtazonosítóhoz és fürtcsomószámhoz. Ezzel az információval a független szoftverszállítók ezután *fürtként*köthetik össze az összetevőlicencét egy Azure-SSIS-ir-hez. Ez a kötés olyan azonosítót használ, amely nem változik az ügyfelek indításakor vagy leállításakor, a fel- és leskálázáskor, a be- és kiskálázáskor, illetve az Azure-SSIS-ir bármilyen módon történő újrakonfigurálásakor.

Az alábbi ábra az új változókat használó külső összetevők tipikus telepítési, aktiválási és licenckötési és érvényesítési folyamatait mutatja be:

![Licencelt alkatrészek telepítése](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Utasítások
1. Az isv-k különböző sk-ekben vagy szinteken kínálhatnak licencelt összetevőket (például egyetlen csomópont, legfeljebb 5 csomópont, legfeljebb 10 csomópont stb.). The ISV provides the corresponding Product Key when customers purchase a product. The ISV can also provide an Azure Storage blob container that contains an ISV Setup script and associated files. Az ügyfelek ezeket a fájlokat a saját tárolójukba másolhatják, és saját termékkulcsukkal módosíthatják őket (például a futtatással). `IsvSetup.exe -pid xxxx-xxxx-xxxx` Az ügyfelek ezután kiépíthetik vagy újrakonfigurálhatják az Azure-SSIS-alapú hitelesítésszolgáltatót a tárolójuk SAS URI-jával paraméterként. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Az Azure-SSIS ir kiépítésekor vagy újrakonfigurálásakor az ISV telepítője minden `SSIS_CLUSTERID` csomóponton fut a Windows környezeti változóinak lekérdezéséhez, és `SSIS_CLUSTERNODECOUNT`. Ezután az Azure-SSIS infravörös küldi a fürtazonosítóját és a termékkulcs a licencelt termék az ISV aktiválási kiszolgáló egy aktiválási kulcs létrehozásához.

3. Az aktiválási kulcs kézhezvétele után az ISV telepítője helyileg tárolhatja a kulcsot az egyes csomópontokon (például a beállításjegyzékben).

4. Amikor az ügyfelek olyan csomagot futtatnak, amely az ISV licenccel rendelkező összetevőjét használja az Azure-SSIS ir csomópontján, a csomag beolvassa a helyileg tárolt aktiválási kulcsot, és ellenőrzi azt a csomópont fürtazonosítójával. A csomag szükség esetén jelentheti a fürtcsomópont-számot az ISV-aktiválási kiszolgálónak is.

    Íme egy példa az aktiválási kulcsot érvényesítő és a fürtcsomópont számát jelző kódra:

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

A blogbejegyzés végén megtalálhatja azoknak a független független adó-partnereknek a listáját, akik összetevőiket és bővítményeiket az Azure-SSIS ir-hoz adaptálták a következő blogbejegyzés végén – [Enterprise Edition, Egyéni telepítés és Harmadik fél általi bővíthetőség az ADF-ben](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>További lépések

-   [Egyéni beállítás az Azure-SSIS integrációs futásórához](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Az Azure-SSIS integrációs futásidejű enterprise kiadása](how-to-configure-azure-ssis-ir-enterprise-edition.md)
