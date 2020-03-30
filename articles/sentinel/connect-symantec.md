---
title: A Symantec ICDx-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Megtudhatja, hogy miként csatlakoztathatja a Symantec ICDx-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588093"
---
# <a name="connect-your-symantec-icdx-appliance"></a>A Symantec ICDx készülék csatlakoztatása 



A Symantec ICDx-összekötő lehetővé teszi, hogy könnyedén csatlakoztatja az összes Symantec biztonsági megoldásnaplóját az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozzon létre, és javítsa a vizsgálatot. Ez több betekintést nyújt a szervezet hálózatába, és javítja a biztonsági műveleti képességeket. A Symantec ICDx és az Azure Sentinel integrációja a REST API-t használja.


> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-symantec-icdx"></a>A Symantec ICDx konfigurálása és csatlakoztatása 

A Symantec ICDx közvetlenül integrálhatja és exportálhatja a naplókat az Azure Sentinelbe.

1. Nyissa meg az ICDx Management Console-t a Microsoft Azure Sentinel (Log Analytics) továbbítók hozzáadásához.
2. Az ICDx navigációs sávján kattintson a **Konfiguráció gombra.** 
3. A **Konfigurációs** képernyő tetején kattintson a **Továbbítók**gombra.
4. A **Továbbítók**csoportban a Microsoft Azure Sentinel (Log Analytics) csoportban kattintson a **Hozzáadás**gombra. 
4. A **Microsoft Azure Sentinel (Log Analytics)** ablakban kattintson a **Speciális megjelenítése gombra.** 
5. A Microsoft Azure Sentinel (Log Analytics) ablakának tetején tegye a következőket:
    -   **Név**: Írja be a 30 karakternél nem hosszabb továbbító nevét. Válasszon egyedi, tartalmas nevet. Ez a név megjelenik a **továbbítók** listájában a Konfiguráció képernyőn és az **irányítópultokon** az Irányítópult képernyőn. Például: Microsoft Azure Log Analytics East. A mező kitöltése kötelező.
    -   **Leírás**: Írja be a szállítmányó leírását. Ez a leírás a **konfigurációs** képernyőn a továbbítók listájában is megjelenik. Adja meg a részleteket, például a továbbított esemény típusát és az adatok vizsgálatához szükséges csoportot.
    -   **Indítástípusa**: Válassza ki a továbbító konfigurációjának indítási módját. A lehetőségek kézi és automatikus.<br>Az alapértelmezett érték az Automatikus. 
6. Az **Események**csoportban tegye a következőket: 
    - **Forrás**: Válassza ki azt a vagy több archívumot, amelyből eseményeket szeretne továbbítani. Kiválaszthatja az aktív gyűjtő archívumok (beleértve a Közös Archívum), árva gyűjtő archívumok (azaz archívumok a gyűjtők, hogy a törölt), ICDx vevő archívumok, vagy a Rendszer Archívum. <br>Az alapértelmezett beállítás a Közös archívum.
      > [!NOTE]
      > ICDx vevő archívumok külön-külön vannak felsorolva, név szerint. 
 
    - **Szűrő**: Adjon hozzá egy szűrőt, amely meghatározza a továbbítandó események részhalmazát. Tegye a következők egyikét:
        - Szűrőfeltétel kijelöléséhez kattintson a Típus, Az Attribútum, az Operátor és az Érték parancsra. 
        - A Szűrő mezőben tekintse át a szűrőfeltételt. Közvetlenül szerkesztheti a mezőben, vagy szükség szerint törölheti.
        - Kattintson az ÉS vagy a VAGY gombra a szűrőfeltételhez való hozzáadáshoz.
        - Mentett lekérdezések alkalmazásához a Mentett lekérdezések elemre is kattintva.
    - **Tartalmazza az attribútumokat:** Írja be a továbbított adatokba felvenni rekettel körülvett attribútumok vesszővel tagolt listáját. A mellékelt attribútumok elsőbbséget élveznek a kizárt attribútumokkal szemben.
    - **Kizárt attribútumok**: Írja be a továbbított adatokból kizárandó attribútumok vesszővel tagolt listáját.
    - **Kötegméret:** Adja meg a kötegenként küldni kívánt események számát. A lehetőségek 10, 50, 100, 500 és 1000.<br>Az alapértelmezett érték 100. 
    - **Díjkorlát**: Válassza ki az események másodpercenkénti mértékén kifejezett továbbításának sebességét. A lehetőségek korlátlan, 500, 1000, 5000, 10000. <br> Az alapértelmezett érték 5000. 
7. Az **Azure Destination csoportban**tegye a következőket: 
    - **Munkaterület-azonosító:** Illessze be a munkaterület-azonosítót alulról. A mező kitöltése kötelező.
    - **Elsődleges kulcs:** Illessze alulról az elsődleges kulcsot. A mező kitöltése kötelező.
    - **Egyéni naplónév:** Írja be az egyéni napló nevét a Microsoft Azure Portal Log Analytics munkaterületére, amelyre eseményeket továbbít. Az alapértelmezett érték a SymantecICDx. A mező kitöltése kötelező.
8. A *továbbító* konfigurációjának befejezéséhez kattintson a Mentés gombra. 
9. A továbbító elindításához kattintson a **Beállítások**csoportban az **Egyebek,** majd a **Start gombra.**
10. Ha a Symantec ICDx-események hez a Log Analytics megfelelő sémáját szeretné használni, keresse meg **a SymantecICDx_CL.**


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Symantec ICDx-et az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


