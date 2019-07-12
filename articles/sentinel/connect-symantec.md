---
title: Csatlakozás Azure-on Előzetesben Sentinel-Symantec ICDx adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Symantec ICDx adatokat az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673757"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Csatlakozás a Symantec ICDx berendezés 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDx összekötő segítségével kapcsolódhat egyszerűen azokhoz az összes a Symantec biztonsági megoldásainak naplóit az Azure Sentinel, megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat, és a vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket. Symantec ICDx és Sentinel-Azure közötti integráció lehetővé teszi a REST API-t használja.


> [!NOTE]
> Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="configure-and-connect-symantec-icdx"></a>Beállítása és csatlakozása a Symantec ICDx 

Symantec ICDx is integrálhatja, és közvetlenül az Azure Sentinel-naplók exportálása.

1. Nyissa meg a hozzáadása a Microsoft Azure Sentinel-(Log Analytics) továbbítók ICDx felügyeleti konzolt.
2. ICDx navigációs sávján kattintson **konfigurációs**. 
3. Felső részén a **konfigurációs** kattintson **továbbítók**.
4. A **továbbítók**, mellett a Microsoft Azure Sentinel-(Log Analytics), kattintson **Hozzáadás**. 
4. Az a **a Microsoft Azure Sentinel-(Log Analytics)** ablakban kattintson a **megjelenítése speciális**. 
5. A kibontott tetején a Microsoft Azure Sentinel-(Log Analytics) ablakban tegye a következőt:
    -   **Név**: Adjon meg egy nevet a továbbító, amely legfeljebb 30 karakterből áll. Válasszon egy egyedi, kifejező nevet. Ez a név a továbbítók listájában jelenik meg a **konfigurációs** képernyő és az irányítópultokon a **irányítópult** képernyő. Példa: A Microsoft Azure Log Analytics keleti régiója. Ez a mező kitöltése kötelező.
    -   **Leírás**: Írja be a továbbító leírását. Ez a leírás jelenik meg a továbbítók listájában a **konfigurációs** képernyő. Többek között például az esemény típusa továbbítva és a csoport, amelyet az adatok vizsgálata.
    -   **Indítási típus**: Válassza ki a továbbítási konfigurálóeszköz tartozó indítási mód. A lehetőségek a következők: manuális és automatikus.<br>Alapértelmezés szerint automatikus. 
6. A **események**, tegye a következőket: 
    - **forrás**: Válassza ki egy vagy több archívumot, ahonnan események továbbítására. Kiválaszthatja, hogy aktív gyűjtő archívumok (beleértve a közös archív), az árva gyűjtő archívumok (azaz archívumok a törölt gyűjtők), ICDx fogadó archívumok vagy a rendszer-archívumot. <br>Az alapértelmezett érték a közös archív.
      > [!NOTE]
      > ICDx fogadó archívumok külön-külön neve alapján vannak listázva. 
 
    - **Szűrő**: Adjon hozzá egy szűrőt, amely meghatározza az események továbbítására részhalmazát. Tegye a következők valamelyikét:
        - Egy szűrési feltételt kiválasztásához kattintson a típusa, attribútum, operátort és érték. 
        - A Szűrő mezőbe tekintse át a szűrési feltételt. Közvetlenül szerkesztheti azt a mezőt, vagy szükség szerint törölje.
        - Kattintson és vagy vagy a felvenni a szűrési feltételt.
        - Mentett lekérdezések a alkalmazni egy korábban mentett lekérdezés is kattinthat.
    - **Attribútumok foglalt**: Írja be a továbbított adatok szerepeljenek attribútumok vesszővel tagolt listáját. A belefoglalt attribútumok elsőbbséget élveznek a kizárt attribútumok.
    - **Kizárt attribútumok**: Írja be a továbbított adatok kizárása attribútumok vesszővel tagolt listáját.
    - **Kötegméret**: Válassza ki az események küldése kötegenkénti száma. A lehetőségek a következők: 10, 50, 100, 500 és 1000.<br>Az alapértelmezett érték 100. 
    - **Sebességkorlát**: Jelölje ki, milyen események lesz továbbítva, másodpercenként kifejezve. A következő lehetőségek közül, 500-as, 1000, korlátlan 5000-es, 10000. <br> Az alapértelmezett 5000-es érték. 
7. A **Azure cél**, tegye a következőket: 
    - **Munkaterület-Azonosítót**: Az alábbi illessze be a munkaterület-Azonosítót. Ez a mező kitöltése kötelező.
    - **Elsődleges kulcs**: Illessze be az alábbi elsődleges kulcsot. Ez a mező kitöltése kötelező.
    - **Egyéni napló neve**: Írja be az egyéni napló neve a Microsoft Azure portal Log Analytics-munkaterületet, amelyhez kívánja továbbítsa az eseményeket. Az alapértelmezett érték SymantecICDx. Ez a mező kitöltése kötelező.
8. Kattintson a *mentése* a továbbító konfiguráció befejezéséhez. 
9. A továbbító alatt elindításához **beállítások**, kattintson a **további** , majd **Start**.
10. A Symantec ICDx események Log Analytics használja a megfelelő sémát, keresse meg **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Symantec ICDx Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

