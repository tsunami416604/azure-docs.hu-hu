---
title: Ismert problémák/áttelepítési korlátozások a hibrid mód használatával
description: Ismerje meg az Azure Database Migration Service hibrid módban való használatával kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649602"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Ismert problémák/áttelepítési korlátozások hibrid mód használatával

Az Azure Database Migration Service hibrid módban való használatával kapcsolatos ismert problémákat és korlátozásokat az alábbi szakaszok ismertetik.

## <a name="installer-fails-to-authenticate"></a>A telepítő hitelesítése sikertelen

A tanúsítvány AdApp-ba való feltöltése után akár néhány perc késéssel is hitelesítheti magát az Azure-ral. A telepítő megpróbálja újra próbálkozni némi késsel, de lehetséges, hogy a propagálási késleltetés hosszabb lesz, mint az újrapróbálkozás, és megjelenik egy **FailedToGetAccessTokenException** üzenet. Ha a tanúsítványt a megfelelő AdApp-ba töltötték fel, és a megfelelő AppId-ot a dmsSettings.json fájlban adta meg, próbálja meg újra futtatni a telepítési parancsot.

## <a name="service-offline-after-successful-installation"></a>Szolgáltatás "offline" sikeres telepítés után

Ha a szolgáltatás a telepítési folyamat sikeres befejezése után offline állapotban jelenik meg, próbálkozzon a következő lépésekkel.

1. Az Azure Portalon az Azure Database Migration Service példányában keresse meg a **hibrid** beállítások lapot, majd ellenőrizze, hogy a dolgozó regisztrálva van-e a regisztrált dolgozók rácsának ellenőrzésével.

    A dolgozó állapotának **online**állapotúnak kell lennie, de probléma esetén **offline állapotúnak** tűnhet.

2. A feldolgozó számítógépen ellenőrizze a szolgáltatás állapotát a következő PowerShell-parancs futtatásával:

    ```
    Get-Service Scenario*
    ```

    Ez a parancs a dolgozót futtató Windows-szolgáltatás állapotát adja meg. Csak egyetlen eredmény nek kell lennie. Ha a dolgozó le van állítva, megpróbálhatja újraindítani a következő PowerShell paranccsal:

    ```
    Start-Service Scenario*
    ```

    A szolgáltatást a Windows Services felhasználói felületén is ellenőrizheti.

3. Ha a Windows szolgáltatás a Futás és a Leállított között ciklusok között fut, akkor a dolgozó indítási problémákba ütközik. A probléma meghatározásához ellenőrizze az Azure Database Migration Service hibrid feldolgozói naplóit.

    - A telepítési folyamat naplói abban a mappában tárolódnak, amelyből a telepítő végrehajtható fájlja a "naplók" mappában található.
    - Az Azure Database Migration Service hibrid munkavégző naplói a **WorkerLogs** mappában tárolódnak abban a mappában, amelyben a feldolgozó telepítve van. A hibrid munkavégző naplófájlok alapértelmezett helye a **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Saját aláírt tanúsítvány használata

A GenerateCert művelet által létrehozott tanúsítvány egy önaláírt tanúsítvány, amely a belső biztonsági házirendek alapján nem elfogadható. A tanúsítvány használata helyett saját tanúsítványt adhat meg, és a dmsSettings.json helyen is megadhat ujjlenyomatot. Ezt a tanúsítványt fel kell tölteni az AdApp-ba, és telepíteni kell arra a számítógépre, amelyre az Azure Database Migration Service hibrid feldolgozóját telepíti. Ezután telepítse ezt a tanúsítványt a személyes kulccsal a Helyi számítógép tanúsítványtárolójába.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>A dolgozói szolgáltatás futtatása alacsony jogosultságú fiókként

Alapértelmezés szerint az Azure Database Migration Service hibrid feldolgozó szolgáltatás helyi rendszerfiókként fut. A szolgáltatáshoz használt fiókot mindaddig módosíthatja, amíg a használt fiók rendelkezik hálózati engedélyekkel. A szolgáltatás "futtatásmásként" fiókjának módosításához használja a következő eljárást.

1. Állítsa le a szolgáltatást, akár a Windows Services szolgáltatáson keresztül, akár a PowerShell Stop-Service parancsával.

2. Frissítse a szolgáltatást, hogy egy másik bejelentkezési fiókot használjon.

3. A helyi számítógép-tanúsítványok tanúsítványában adjon személyes kulcsengedélyeket a **DMS hibrid alkalmazáskulcs** és a **DMS-forgatókönyv motorkulcs-pár** tanúsítványainak új fiókjához.

    a. Nyissa meg a certmgr megnyitását a következő kulcsok megtekintéséhez:

    - DMS hibrid alkalmazáskulcs
    - DMS hibrid feldolgozó beállítási kulcsa
    - DMS-forgatókönyv motorkulcs-párja

    b. Kattintson a jobb gombbal a **DMS hibrid alkalmazáskulcs** bejegyzésre, mutasson a **Minden feladat**pontra, és válassza **a Személyes kulcsok kezelése parancsot.**

    c. A **Biztonság** lapon válassza a **Hozzáadás**lehetőséget, majd írja be a fiók nevét.

    d. Ugyanezekkel a lépésekkel adhat személyes kulcsot az új fiókhoz a **DMS-forgatókönyv motorkulcs-pár tanúsítványához.**

## <a name="unregistering-the-worker-manually"></a>A dolgozó manuális regisztrációjának megszüntetése

Ha már nem rendelkezik hozzáféréssel a feldolgozószámítógéphez, a következő lépések végrehajtásával törölje a dolgozó regisztrációját, és újra felhasználja az Azure Database Migration Service-példányt:

1. Az Azure Portalon az Azure Database Migration Service-példányt, majd keresse meg a **hibrid** beállítások lapot.

   A dolgozóbejegyzés megjelenik a listában, és az állapot **offline állapotú.**

2. A munkavégző bejegyzéslista jobb szélén jelölje ki a három pontot, majd kattintson a **Regisztráció visszavonása parancsra.**

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Az egyes áttelepítési forgatókönyvekkel kapcsolatos problémák megoldása

Az alábbi szakaszok az Azure Database Migration Service hibrid mód használatával kapcsolatos forgatókönyv-specifikus problémákat ismertetik az online áttelepítés végrehajtásához.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Online áttelepítések az Azure SQL Database felügyelt példányába

**Magas processzorhasználat**

**Probléma**: Az SQL Database felügyelt példányába való online áttelepítések esetén a hibrid feldolgozót futtató számítógép magas processzorhasználattal találkozik, ha túl sok biztonsági mentés van, vagy ha a biztonsági mentések túl nagyok.

**A**probléma enyhítése érdekében használjon tömörített biztonsági másolatokat, ossza fel az áttelepítést úgy, hogy az több megosztást használjon, vagy méretezhesse fel a hibrid feldolgozót futtató számítógépet.
